- **Introduction to compound data types**
    
    https://www.learncpp.com/cpp-tutorial/introduction-to-compound-data-types/
    
    In lesson [4.1 -- Introduction to fundamental data types](https://www.learncpp.com/cpp-tutorial/introduction-to-fundamental-data-types/), we introduced the fundamental data types, which are the basic data types that C++ provides as part of the core language.
    
    We’ve made much use of these fundamental types in our programs so far, especially the `int` data type. And while these fundamental types are extremely useful for straightforward uses, they don’t cover our full range of needs as we begin to do more complicated things.
    
    For example, imagine you were writing a math program to multiply two fractions. How would you represent a fraction in your program? You might use a pair of integers (one for the numerator, one for the denominator), like this:
    
    ```
    #include <iostream>
    
    int main()
    {
        // Our first fraction
        int num1 {};
        int den1 {};
    
        // Our second fraction
        int num2 {};
        int den2 {};
    
        // Used to eat (remove) the slash between the numerator and denominator
        char ignore {};
    
        std::cout << "Enter a fraction: ";
        std::cin >> num1 >> ignore >> den1;
    
        std::cout << "Enter a fraction: ";
        std::cin >> num2 >> ignore >> den2;
    
        std::cout << "The two fractions multiplied: "
            << num1 * num2 << '/' << den1 * den2 << '\n';
    
        return 0;
    }
    ```
    
    And a run of this program:
    
    ```
    Enter a fraction: 1/2
    Enter a fraction: 3/4
    The two fractions multiplied: 3/8
    
    ```
    
    While this program works, it introduces a couple of challenges for us to improve upon. First, each pair of integers is only loosely linked -- outside of comments and the context of how they are used in the code, there’s little to suggest that each numerator and denominator pair are related. Second, following the DRY (don’t repeat yourself) principle, we should create a function to handle the user inputting a fraction (along with some error handling). However, functions can only return a single value, so how would we return the numerator and denominator back to the caller?
    
    Now imagine another case where you’re writing a program that needs to keep a list of employee IDs. How might you do so? You might try something like this:
    
    ```
    int main()
    {
        int id1 { 42 };
        int id2 { 57 };
        int id3 { 162 };
        // and so on
    }
    ```
    
    But what if you had 100 employees? First, you’d need to type in 100 variable names. And what if you needed to print them all? Or pass them to a function? We’d be in for a lot of typing. This simply doesn’t scale.
    
    Clearly fundamental types will only carry us so far.
    
    Compound data types
    
    Fortunately, C++ supports a second set of data types: **compound data types** (also sometimes called **composite data types**) are types that are defined in terms of other existing data types. Compound data types have additional properties and behaviors that make them useful for solving certain types of problems.
    
    As we’ll show in this chapter and future chapters, we can use compound data types to elegantly solve all of the challenges we presented above.
    
    C++ supports the following compound types:
    
    - Functions
    - C-style Arrays
    - Pointer types:
        - Pointer to object
        - Pointer to function
    - Pointer to member types:
        - Pointer to data member
        - Pointer to member function
    - Reference types:
        - L-value references
        - R-value references
    - Enumerated types:
        - Unscoped enumerations
        - Scoped enumerations
    - Class types:
        - Structs
        - Classes
        - Unions
    
    You’ve already been using one compound type regularly: functions. For example, consider this function:
    
    The type of this function is `void(int, double)`. Note that this type is composed of fundamental types, making it a compound type. Of course, functions also have their own special behaviors as well (e.g. being callable).
    
    Because there’s a lot of material to cover here, we’ll do it over multiple chapters. In this chapter, we’ll cover some of the more straightforward compound types, including `l-value references`, and `pointers`. Next chapter, we’ll cover `unscoped enumerations`, `scoped enumerations`, and our first class type: `structs`. Then, in the chapters beyond that, we’ll introduce classses and dig into some of the more useful `array` types. This includes `std::string` (introduced in lesson [5.7 -- Introduction to std::string](https://www.learncpp.com/cpp-tutorial/introduction-to-stdstring/)), which is actually a class type!
    
    Got your game face on? Let’s go!
    
- **Value categories (lvalues and rvalues)**
    
    https://www.learncpp.com/cpp-tutorial/value-categories-lvalues-and-rvalues/
    
    Before we talk about our first compound type (lvalue references), we’re going to take a little detour and talk about what an `lvalue` is.
    
    In lesson [1.10 -- Introduction to expressions](https://www.learncpp.com/cpp-tutorial/introduction-to-expressions/), we defined an expression as “a combination of literals, variables, operators, and function calls that can be executed to produce a singular value”.
    
    For example:
    
    ```
    #include <iostream>
    
    int main()
    {
        std::cout << 2 + 3 << '\n'; // The expression 2 + 3 produces the value 5
    
        return 0;
    }
    ```
    
    In the above program, the expression `2 + 3` is evaluated to produce the value 5, which is then printed to the console.
    
    In lesson [6.4 -- Increment/decrement operators, and side effects](https://www.learncpp.com/cpp-tutorial/increment-decrement-operators-and-side-effects/), we also noted that expressions can produce side effects that outlive the expression:
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 };
        ++x; // This expression statement has the side-effect of incrementing x
        std::cout << x << '\n'; // prints 6
    
        return 0;
    }
    ```
    
    In the above program, the expression `++x` increments the value of `x`, and that value remains changed even after the expression has finished evaluating.
    
    Besides producing values and side effects, expressions can do one more thing: they can evaluate to objects or functions. We’ll explore this point further in just a moment.
    
    The properties of an expression
    
    To help determine how expressions should evaluate and where they can be used, all expressions in C++ have two properties: a type and a value category.
    
    The type of an expression
    
    The type of an expression is equivalent to the type of the value, object, or function that results from the evaluated expression. For example:
    
    ```
    int main()
    {
        auto v1 { 12 / 4 }; // int / int => int
        auto v2 { 12.0 / 4 }; // double / int => double
    
        return 0;
    }
    ```
    
    For `v1`, the compiler will determine (at compile time) that a division with two `int` operands will produce an `int` result, so `int` is the type of this expression. Via type inference, `int` will then be used as the type of `v1`.
    
    For `v2`, the compiler will determine (at compile time) that a division with a `double` operand and an `int` operand will produce a `double` result. Remember that arithmetic operators must have operands of matching types, so in this case, the `int` operand gets converted to a `double`, and a floating point division is performed. So `double` is the type of this expression.
    
    The compiler can use the type of an expression to determine whether an expression is valid in a given context. For example:
    
    ```
    #include <iostream>
    
    void print(int x)
    {
        std::cout << x << '\n';
    }
    
    int main()
    {
        print("foo"); // error: print() was expecting an int argument, we tried to pass in a string literal
    
        return 0;
    }
    ```
    
    In the above program, the `print(int)` function is expecting an `int` parameter. However, the type of the expression we’re passing in (the string literal `"foo"`) does not match, and no conversion can be found. So a compile error results.
    
    Note that the type of an expression must be determinable at compile time (otherwise type checking and type deduction wouldn’t work) -- however, the value of an expression may be determined at either compile time (if the expression is constexpr) or runtime (if the expression is not constexpr).
    
    The value category of an expression
    
    Now consider the following program:
    
    ```
    int main()
    {
        int x{};
    
        x = 5; // valid: we can assign 5 to x
        5 = x; // error: can not assign value of x to literal value 5
    
        return 0;
    }
    ```
    
    One of these assignment statements is valid (assigning value `5` to variable `x`) and one is not (what would it mean to assign the value of `x` to the literal value `5`?). So how does the compiler know which expressions can legally appear on either side of an assignment statement?
    
    The answer lies in the second property of expressions: the `value category`. The **value category** of an expression (or subexpression) indicates whether an expression resolves to a value, a function, or an object of some kind.
    
    Prior to C++11, there were only two possible value categories: `lvalue` and `rvalue`.
    
    In C++11, three additional value categories (`glvalue`, `prvalue`, and `xvalue`) were added to support a new feature called `move semantics`.
    
    Author’s note
    
    In this lesson, we’ll stick to the pre-C++11 view of value categories, as this makes for a gentler introduction to value categories (and is all that we need for the moment). We’ll cover move semantics (and the additional three value categories) in a future chapter.
    
    Lvalue and rvalue expressions
    
    An **lvalue** (pronounced “ell-value”, short for “left value” or “locator value”, and sometimes written as “l-value”) is an expression that evaluates to an identifiable object or function (or bit-field).
    
    The term “identity” is used by the C++ standard, but is not well-defined. An entity (such as an object or function) that has an identity can be differentiated from other similar entities (typically by comparing the addresses of the entity).
    
    Entities with identities can be accessed via an identifier, reference, or pointer, and typically have a lifetime longer than a single expression or statement.
    
    ```
    int main()
    {
        int x { 5 };
        int y { x }; // x is an lvalue expression
    
        return 0;
    }
    ```
    
    In the above program, the expression `x` is an lvalue expression as it evaluates to variable `x` (which has an identifier).
    
    Since the introduction of constants into the language, lvalues come in two subtypes: a **modifiable lvalue** is an lvalue whose value can be modified. A **non-modifiable lvalue** is an lvalue whose value can’t be modified (because the lvalue is const or constexpr).
    
    ```
    int main()
    {
        int x{};
        const double d{};
    
        int y { x }; // x is a modifiable lvalue expression
        const double e { d }; // d is a non-modifiable lvalue expression
    
        return 0;
    }
    ```
    
    An **rvalue** (pronounced “arr-value”, short for “right value”, and sometimes written as `r-value`) is an expression that is not an lvalue. Rvalue expressions evaluate to a value. Commonly seen rvalues include literals (except C-style string literals, which are lvalues) and the return value of functions and operators that return by value. Rvalues aren’t identifiable (meaning they have to be used immediately), and only exist within the scope of the expression in which they are used.
    
    ```
    int return5()
    {
        return 5;
    }
    
    int main()
    {
        int x{ 5 }; // 5 is an rvalue expression
        const double d{ 1.2 }; // 1.2 is an rvalue expression
    
        int y { x }; // x is a modifiable lvalue expression
        const double e { d }; // d is a non-modifiable lvalue expression
        int z { return5() }; // return5() is an rvalue expression (since the result is returned by value)
    
        int w { x + 1 }; // x + 1 is an rvalue expression
        int q { static_cast<int>(d) }; // the result of static casting d to an int is an rvalue expression
    
        return 0;
    }
    ```
    
    You may be wondering why `return5()`, `x + 1`, and `static_cast<int>(d)` are rvalues: the answer is because these expressions produce temporary values that are not identifiable objects.
    
    Key insight
    
    Lvalue expressions evaluate to an identifiable object.
    
    Rvalue expressions evaluate to a value.
    
    Value categories and operators
    
    Unless otherwise specified, operators expect their operands to be rvalues. For example, binary `operator+` expects its operands to be rvalues:
    
    ```
    #include <iostream>
    
    int main()
    {
        std::cout << 1 + 2; // 1 and 2 are rvalues, operator+ returns an rvalue
    
        return 0;
    }
    ```
    
    The literals `1` and `2` are both rvalue expressions. `operator+` will happily use these to return the rvalue expression `3`.
    
    Now we can answer the question about why `x = 5` is valid but `5 = x` is not: an assignment operation requires its left operand to be a modifiable lvalue expression. The latter assignment (`5 = x`) fails because the left operand expression `5` is an rvalue, not a modifiable lvalue.
    
    ```
    int main()
    {
        int x{};
    
        // Assignment requires the left operand to be a modifiable lvalue expression and the right operand to be an rvalue expression
        x = 5; // valid: x is a modifiable lvalue expression and 5 is an rvalue expression
        5 = x; // error: 5 is an rvalue expression and x is a modifiable lvalue expression
    
        return 0;
    }
    ```
    
    Lvalue-to-rvalue conversion
    
    Since assignment operations expect the right operand to be an rvalue expression, you might be wondering why the following works:
    
    ```
    int main()
    {
        int x{ 1 };
        int y{ 2 };
    
        x = y; // y is not an rvalue, but this is legal
    
        return 0;
    }
    ```
    
    In cases where an rvalue is expected but an lvalue is provided, the lvalue will undergo an lvalue-to-rvalue conversion so that it can be used in such contexts. This basically means the lvalue is evaluated to produce its value, which is an rvalue.
    
    In the above example, the lvalue expression `y` undergoes an lvalue-to-rvalue conversion, which evaluates `y` to produce an rvalue (`2`), which is then assigned to `x`.
    
    Key insight
    
    An lvalue will implicitly convert to an rvalue. This means an lvalue can be used anywhere an rvalue is expected.
    
    An rvalue, on the other hand, will not implicitly convert to an lvalue.
    
    Now consider this example:
    
    ```
    int main()
    {
        int x { 2 };
    
        x = x + 1;
    
        return 0;
    }
    ```
    
    In this statement, the variable `x` is being used in two different contexts. On the left side of the assignment operator (where an lvalue expression is required), `x` is an lvalue expression that evaluates to variable `x`. On the right side of the assignment operator, `x` undergoes an lvalue-to-rvalue conversion and is then evaluated so that its (`2`) can be used as the left operand of `operator+`. `operator+` returns the rvalue expression `3`, which is then used as the right operand for the assignment.
    
    How to differentiate lvalues and rvalues
    
    You may still be confused about what kind of expressions qualify as an lvalue vs an rvalue. For example, is the result of `operator++` an lvalue or an rvalue? We’ll cover various methods you can use to determine which is which here.
    
    Tip
    
    A rule of thumb to identify lvalue and rvalue expressions:
    
    - Lvalue expressions are those that evaluate to functions or identifiable objects (including variables) that persist beyond the end of the expression.
    - Rvalue expressions are those that evaluate to values, including literals and temporary objects that do not persist beyond the end of the expression.
    
    For a more complete list of lvalue and rvalue expressions, you can consult technical documentation.
    
    Tip
    
    A full list of lvalue and rvalue expressions can be found [here](https://en.cppreference.com/w/cpp/language/value_category). In C++11, rvalues are broken into two subtypes: prvalues and xvalues, so the rvalues we’re talking about here are the sum of both of those categories.
    
    Finally, we can write a program and have the compiler tell us what kind of expression something is. The following code demonstrates a method that determines whether an expression is an lvalue or an rvalue:
    
    ```
    #include <iostream>
    #include <string>
    
    // T& is an lvalue reference, so this overload will be preferred for lvalues
    template <typename T>
    constexpr bool is_lvalue(T&)
    {
        return true;
    }
    
    // T&& is an rvalue reference, so this overload will be preferred for rvalues
    template <typename T>
    constexpr bool is_lvalue(T&&)
    {
        return false;
    }
    
    // A helper macro (#expr prints whatever is passed in for expr as text)
    #define PRINTVCAT(expr) { std::cout << #expr << " is an " << (is_lvalue(expr) ? "lvalue\n" : "rvalue\n"); }
    
    int getint() { return 5; }
    
    int main()
    {
        PRINTVCAT(5);        // rvalue
        PRINTVCAT(getint()); // rvalue
        int x { 5 };
        PRINTVCAT(x);        // lvalue
        PRINTVCAT(std::string {"Hello"}); // rvalue
        PRINTVCAT("Hello");  // lvalue
        PRINTVCAT(++x);      // lvalue
        PRINTVCAT(x++);      // rvalue
    }
    ```
    
    This prints:
    
    ```
    5 is an rvalue
    getint() is an rvalue
    x is an lvalue
    std::string {"Hello"} is an rvalue
    "Hello" is an lvalue
    ++x is an lvalue
    x++ is an rvalue
    
    ```
    
    This method relies on two overloaded functions: one with an lvalue refrence parameter and one with an rvalue reference parameter. The lvalue reference version will be preferred for lvalue arguments, and the rvalue reference version will be preferred for rvalue arguments. Thus we can determine whether the argument is an lvalue or rvalue based on which function gets selected.
    
    So as you can see, whether `operator++` results in an lvalue or an rvalue depends on whether it is used as a prefix operator (which returns an lvalue) or a postfix operator (which returns an rvalue)!
    
    For advanced readers
    
    Unlike the other literals (which are rvalues), a C-style string literal is an lvalue because C-style strings (which are C-style arrays) decay to a pointer. The decay process only works if the array is an lvalue (and thus has an address that can be stored in the pointer). C++ inherited this for backwards compatibility.
    
    We cover array decay in lesson [17.8 -- C-style array decay](https://www.learncpp.com/cpp-tutorial/c-style-array-decay/).
    
- **Lvalue references**
    
    https://www.learncpp.com/cpp-tutorial/lvalue-references/
    
    In C++, a **reference** is an alias for an existing object. Once a reference has been defined, any operation on the reference is applied to the object being referenced. This means we can use a reference to read or modify the object being referenced.
    
    Although references might seem silly, useless, or redundant at first, references are used everywhere in C++ (we’ll see examples of this in a few lessons).
    
    Key insight
    
    A reference is essentially identical to the object being referenced.
    
    You can also create references to functions, though this is done less often.
    
    Modern C++ contains two types of references: lvalue references, and rvalue references. In this chapter, we’ll discuss lvalue references.
    
    Related content
    
    Because we’ll be talking about lvalues and rvalues in this lesson, please review [12.2 -- Value categories (lvalues and rvalues)](https://www.learncpp.com/cpp-tutorial/value-categories-lvalues-and-rvalues/) if you need a refresher on these terms before proceeding. Rvalue references are covered in the chapter on move semantics ([chapter 22](https://www.learncpp.com/#Chapter22)).
    
    Lvalue references types
    
    An **lvalue reference** (commonly just called a “reference” since prior to C++11 there was only one type of reference) acts as an alias for an existing lvalue (such as a variable).
    
    Just like the type of an object determines what kind of value it can hold, the type of a reference determines what type of object it can reference. Lvalue reference types can be identified by use of a single ampersand (&) in the type specifier:
    
    ```
    // regular types
    int        // a normal int type (not an reference)
    int&       // an lvalue reference to an int object
    double&    // an lvalue reference to a double object
    const int& // an lvalue reference to a const int object
    ```
    
    For example, `int&` is the type of an lvalue reference to an `int` object, and `const int&` is the type of an lvalue reference to a `const int` object.
    
    A type that specifies a reference (e.g. `int&`) is called a **reference type**. The type that can be referenced (e.g. `int`) is called the **referenced type**.
    
    Nomenclature
    
    There are two kinds of lvalue references:
    
    - An lvalue reference to a non-const is commonly just called an “lvalue reference”, but may also be referred to as an **lvalue reference to non-const** or a **non-const lvalue reference** (since it isn’t defined using the `const` keyword).
    - An lvalue reference to a const is commonly called either an **lvalue reference to const** or a **const lvalue reference**.
    
    We’ll focus on non-const lvalue references in this lesson, and on const lvalue references in the next lesson ([12.4 -- Lvalue references to const](https://www.learncpp.com/cpp-tutorial/lvalue-references-to-const/)).
    
    Lvalue reference variables
    
    One of the things we can do with an lvalue reference type is create an lvalue reference variable. An **lvalue reference variable** is a variable that acts as a reference to an lvalue (usually another variable).
    
    To create an lvalue reference variable, we simply define a variable with an lvalue reference type:
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 };    // x is a normal integer variable
        int& ref { x }; // ref is an lvalue reference variable that can now be used as an alias for variable x
    
        std::cout << x << '\n';  // print the value of x (5)
        std::cout << ref << '\n'; // print the value of x via ref (5)
    
        return 0;
    }
    ```
    
    In the above example, the type `int&` defines `ref` as an lvalue reference to an int, which we then initialize with lvalue expression `x`. Thereafter, `ref` and `x` can be used synonymously. This program thus prints:
    
    ```
    5
    5
    
    ```
    
    From the compiler’s perspective, it doesn’t matter whether the ampersand is “attached” to the type name (`int& ref`) or the variable’s name (`int &ref`), and which you choose is a matter of style. Modern C++ programmers tend to prefer attaching the ampersand to the type, as it makes clearer that the reference is part of the type information, not the identifier.
    
    Best practice
    
    When defining a reference, place the ampersand next to the type (not the reference variable’s name).
    
    For advanced readers
    
    For those of you already familiar with pointers, the ampersand in this context does not mean “address of”, it means “lvalue reference to”.
    
    Modifying values through a non-const lvalue reference
    
    In the above example, we showed that we can use a reference to read the value of the object being referenced. We can also use a non-const reference to modify the value of the object being referenced:
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 }; // normal integer variable
        int& ref { x }; // ref is now an alias for variable x
    
        std::cout << x << ref << '\n'; // print 55
    
        x = 6; // x now has value 6
    
        std::cout << x << ref << '\n'; // prints 66
    
        ref = 7; // the object being referenced (x) now has value 7
    
        std::cout << x << ref << '\n'; // prints 77
    
        return 0;
    }
    ```
    
    This code prints:
    
    ```
    55
    66
    77
    
    ```
    
    In the above example, `ref` is an alias for `x`, so we are able to change the value of `x` through either `x` or `ref`.
    
    Reference initialization
    
    Much like constants, all references must be initialized. References are initialized using a form of initialization called **reference initialization**.
    
    ```
    int main()
    {
        int& invalidRef;   // error: references must be initialized
    
        int x { 5 };
        int& ref { x }; // okay: reference to int is bound to int variable
    
        return 0;
    }
    ```
    
    When a reference is initialized with an object (or function), we say it is **bound** to that object (or function). The process by which such a reference is bound is called **reference binding**. The object (or function) being referenced is sometimes called the **referent**.
    
    Non-const lvalue references can only be bound to a *modifiable* lvalue.
    
    ```
    int main()
    {
        int x { 5 };
        int& ref { x };         // okay: non-const lvalue reference bound to a modifiable lvalue
    
        const int y { 5 };
        int& invalidRef { y };  // invalid: non-const lvalue reference can't bind to a non-modifiable lvalue
        int& invalidRef2 { 0 }; // invalid: non-const lvalue reference can't bind to an rvalue
    
        return 0;
    }
    ```
    
    Key insight
    
    If non-const lvalue references could be bound to non-modifiable (const) lvalues or rvalues, then you would be able to change those values through the reference, which would be a violation of their const-ness.
    
    Lvalue references to `void` are disallowed (what would be the point?).
    
    Even though the type of the reference (e.g. `int&`) doesn’t exactly match the type of the object being bound (e.g. `int`), no conversion is applied here (not even a trivial conversion) -- the difference in types is handled as part of the reference initialization process.
    
    A reference will (usually) only bind to an object matching its referenced type
    
    In most cases, a reference will only bind to an object whose type matches the referenced type, (there are some exceptions to this rule that we’ll discuss when we get into inheritance).
    
    If you try to bind a reference to an object that does not match its referenced type, the compiler will try to implicitly convert the object to the referenced type and then bind the reference to that.
    
    Key insight
    
    Since the result of a conversion is an rvalue, and a non-const lvalue reference can’t bind to an rvalue, trying to bind a non-const lvalue reference to an object that does not match its referenced type will result in a compilation error.
    
    ```
    int main()
    {
        int x { 5 };
        int& ref { x };            // okay: referenced type (int) matches type of initializer
    
        double d { 6.0 };
        int& invalidRef { d };     // invalid: conversion of double to int is narrowing conversion, disallowed by list initialization
        double& invalidRef2 { x }; // invalid: non-const lvalue reference can't bind to rvalue (result of converting x to double)
    
        return 0;
    }
    ```
    
    References can’t be reseated (changed to refer to another object)
    
    Once initialized, a reference in C++ cannot be **reseated**, meaning it cannot be changed to reference another object.
    
    New C++ programmers often try to reseat a reference by using assignment to provide the reference with another variable to reference. This will compile and run -- but not function as expected. Consider the following program:
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 };
        int y { 6 };
    
        int& ref { x }; // ref is now an alias for x
    
        ref = y; // assigns 6 (the value of y) to x (the object being referenced by ref)
        // The above line does NOT change ref into a reference to variable y!
    
        std::cout << x << '\n'; // user is expecting this to print 5
    
        return 0;
    }
    ```
    
    Perhaps surprisingly, this prints:
    
    ```
    6
    
    ```
    
    When a reference is evaluated in an expression, it resolves to the object it’s referencing. So `ref = y` doesn’t change `ref` to now reference `y`. Rather, because `ref` is an alias for `x`, the expression evaluates as if it was written `x = y` -- and since `y` evaluates to value `6`, `x` is assigned the value `6`.
    
    Reference scope and duration
    
    Reference variables follow the same scoping and duration rules that normal variables do:
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 }; // normal integer
        int& ref { x }; // reference to variable value
    
         return 0;
    } // x and ref die here
    ```
    
    References and referents have independent lifetimes
    
    With one exception (that we’ll cover next lesson), the lifetime of a reference and the lifetime of its referent are independent. In other words, both of the following are true:
    
    - A reference can be destroyed before the object it is referencing.
    - The object being referenced can be destroyed before the reference.
    
    When a reference is destroyed before the referent, the referent is not impacted. The following program demonstrates this:
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 };
    
        {
            int& ref { x };   // ref is a reference to x
            std::cout << ref << '\n'; // prints value of ref (5)
        } // ref is destroyed here -- x is unaware of this
    
        std::cout << x << '\n'; // prints value of x (5)
    
        return 0;
    } // x destroyed here
    ```
    
    The above prints:
    
    ```
    5
    5
    
    ```
    
    When `ref` dies, variable `x` carries on as normal, blissfully unaware that a reference to it has been destroyed.
    
    Dangling references
    
    When an object being referenced is destroyed before a reference to it, the reference is left referencing an object that no longer exists. Such a reference is called a **dangling reference**. Accessing a dangling reference leads to undefined behavior.
    
    Dangling references are fairly easy to avoid, but we’ll show a case where this can happen in practice in lesson [12.12 -- Return by reference and return by address](https://www.learncpp.com/cpp-tutorial/return-by-reference-and-return-by-address/).
    
    References aren’t objects
    
    Perhaps surprisingly, references are not objects in C++. A reference is not required to exist or occupy storage. If possible, the compiler will optimize references away by replacing all occurrences of a reference with the referent. However, this isn’t always possible, and in such cases, references may require storage.
    
    This also means that the term “reference variable” is a bit of a misnomer, as variables are objects with a name, and references aren’t objects.
    
    Because references aren’t objects, they can’t be used anywhere an object is required (e.g. you can’t have a reference to a reference, since an lvalue reference must reference an identifiable object). In cases where you need a reference that is an object or a reference that can be reseated, `std::reference_wrapper` (which we cover in lesson [23.3 -- Aggregation](https://www.learncpp.com/cpp-tutorial/aggregation/)) provides a solution.
    
    As an aside…
    
    Consider the following variables:
    
    ```
    int var{};
    int& ref1{ var };  // an lvalue reference bound to var
    int& ref2{ ref1 }; // an lvalue reference bound to var
    ```
    
    Because `ref2` (a reference) is initialized with `ref1` (a reference), you might be tempted to conclude that `ref2` is a reference to a reference. It is not. Because `ref1` is a reference to `var`, when used in an expression (such as an initializer), `ref1` evaluates to `var`. So `ref2` is just a normal lvalue reference (as indicated by its type `int&`), bound to `var`.
    
    A reference to a reference (to an `int`) would have syntax `int&&` -- but since C++ doesn’t support references to references, this syntax was repurposed in C++11 to indicate an rvalue reference (which we cover in lesson [22.2 -- R-value references](https://www.learncpp.com/cpp-tutorial/rvalue-references/)).
    
    Author’s note
    
    If references seem a bit useless at this point, don’t worry. References are used a lot, and we’ll cover one of the primary reasons why shortly, in lessons [12.5 -- Pass by lvalue reference](https://www.learncpp.com/cpp-tutorial/pass-by-lvalue-reference/) and [12.6 -- Pass by const lvalue reference](https://www.learncpp.com/cpp-tutorial/pass-by-const-lvalue-reference/).
    
    Quiz time
    
    Question #1
    
    Determine what values the following program prints by yourself (do not compile the program).
    
    ```
    #include <iostream>
    
    int main()
    {
        int x{ 1 };
        int& ref{ x };
    
        std::cout << x << ref << '\n';
    
        int y{ 2 };
        ref = y;
        y = 3;
    
        std::cout << x << ref << '\n';
    
        x = 4;
    
        std::cout << x << ref << '\n';
    
        return 0;
    }
    ```
    
- **Lvalue references to const**
    
    https://www.learncpp.com/cpp-tutorial/lvalue-references-to-const/
    
    In the previous lesson ([12.3 -- Lvalue references](https://www.learncpp.com/cpp-tutorial/lvalue-references/)), we discussed how an lvalue reference can only bind to a modifiable lvalue. This means the following is illegal:
    
    ```
    int main()
    {
        const int x { 5 }; // x is a non-modifiable (const) lvalue
        int& ref { x }; // error: ref can not bind to non-modifiable lvalue
    
        return 0;
    }
    ```
    
    This is disallowed because it would allow us to modify a const variable (`x`) through the non-const reference (`ref`).
    
    But what if we want to have a const variable we want to create a reference to? A normal lvalue reference (to a non-const value) won’t do.
    
    Lvalue reference to const
    
    By using the `const` keyword when declaring an lvalue reference, we tell an lvalue reference to treat the object it is referencing as const. Such a reference is called an **lvalue reference to a const value** (sometimes called a **reference to const** or a **const reference**).
    
    Lvalue references to const can bind to non-modifiable lvalues:
    
    ```
    int main()
    {
        const int x { 5 };    // x is a non-modifiable lvalue
        const int& ref { x }; // okay: ref is a an lvalue reference to a const value
    
        return 0;
    }
    ```
    
    Because lvalue references to const treat the object they are referencing as const, they can be used to access but not modify the value being referenced:
    
    ```
    #include <iostream>
    
    int main()
    {
        const int x { 5 };    // x is a non-modifiable lvalue
        const int& ref { x }; // okay: ref is a an lvalue reference to a const value
    
        std::cout << ref << '\n'; // okay: we can access the const object
        ref = 6;                  // error: we can not modify an object through a const reference
    
        return 0;
    }
    ```
    
    Initializing an lvalue reference to const with a modifiable lvalue
    
    Lvalue references to const can also bind to modifiable lvalues. In such a case, the object being referenced is treated as const when accessed through the reference (even though the underlying object is non-const):
    
    ```
    #include <iostream>
    
    int main()
    {
        int x { 5 };          // x is a modifiable lvalue
        const int& ref { x }; // okay: we can bind a const reference to a modifiable lvalue
    
        std::cout << ref << '\n'; // okay: we can access the object through our const reference
        ref = 7;                  // error: we can not modify an object through a const reference
    
        x = 6;                // okay: x is a modifiable lvalue, we can still modify it through the original identifier
    
        return 0;
    }
    ```
    
    In the above program, we bind const reference `ref` to modifiable lvalue `x`. We can then use `ref` to access `x`, but because `ref` is const, we can not modify the value of `x` through `ref`. However, we still can modify the value of `x` directly (using the identifier `x`).
    
    Best practice
    
    Favor `lvalue references to const` over `lvalue references to non-const` unless you need to modify the object being referenced.
    
    Initializing an lvalue reference to const with an rvalue
    
    Perhaps surprisingly, lvalues references to const can also bind to rvalues:
    
    ```
    #include <iostream>
    
    int main()
    {
        const int& ref { 5 }; // okay: 5 is an rvalue
    
        std::cout << ref << '\n'; // prints 5
    
        return 0;
    }
    ```
    
    When this happens, a temporary object is created and initialized with the rvalue, and the reference to const is bound to that temporary object.
    
    Related content
    
    We covered temporary objects in lesson [2.5 -- Introduction to local scope](https://www.learncpp.com/cpp-tutorial/introduction-to-local-scope/).
    
    Initializing an lvalue reference to const with a value of a different type
    
    Lvalue references to const can even bind to values of a different type, so long as those values can be implicitly converted to the reference type:
    
    ```
    #include <iostream>
    
    int main()
    {
        // case 1
        const double& r1 { 5 };  // temporary double initialized with value 5, r1 binds to temporary
    
        std::cout << r1 << '\n'; // prints 5
    
        // case 2
        char c { 'a' };
        const int& r2 { c };     // temporary int initialized with value 'a', r2 binds to temporary
    
        std::cout << r2 << '\n'; // prints 97 (since r2 is a reference to int)
    
        return 0;
    }
    ```
    
    In case 1, a temporary object of type `double` is created and initialized with int value `5`. Then `const double& r1` is bound to that temporary double object.
    
    In case 2, a temporary object of type `int` is created and initialized with char value `a`. Then `const int& r2` is bound to that temporary int object.
    
    In both cases, the type of the reference and the type of the temporary match.
    
    Key insight
    
    If you try to bind a const lvalue reference to a value of a different type, the compiler will create a temporary object of the same type as the reference, initialize it using the value, and then bind the reference to the temporary.
    
    Also note that when we print `r2` it prints as an int rather than a char. This is because `r2` is a reference to an int object (the temporary int that was created), not to char `c`.
    
    Although it may seem strange to allow this, we’ll see examples where this is useful in lesson [12.6 -- Pass by const lvalue reference](https://www.learncpp.com/cpp-tutorial/pass-by-const-lvalue-reference/).
    
    Warning
    
    We normally assume that a reference is identical to the object it is bound to -- but this assumption is broken when a reference is bound to a temporary copy of the object or a temporary resulting from the conversion of the object instead. Any modifications subsequently made to the original object will not be seen by the reference (as it is referencing a different object), and vice-versa.
    
    Here’s a silly example showing this:
    
    ```
    #include <iostream>
    
    int main()
    {
        short bombs { 1 };         // I can has bomb! (note: type is short)
    
        const int& you { bombs };  // You can has bomb too (note: type is int&)
        --bombs;                   // Bomb all gone
    
        if (you)                   // You still has?
        {
            std::cout << "Bombs away!  Goodbye, cruel world.\n"; // Para bailar la bomba
        }
    
        return 0;
    }
    ```
    
    In the above example, `bombs` is a `short` and `you` is a `const int&`. Because `you` can only bind to an `int` object, when `you` is initialized with `bombs`, the compiler will implicitly convert `bombs` to an `int`, which results in the creation of a temporary `int` object (with value `1`). `you` ends up bound to this temporary object rather than `bombs`.
    
    When `bombs` is decremented, `you` is not affected because it is referencing a different object. So although we expect `if (you)` to evaluate to `false`, it actually evaluates to `true`.
    
    If you would stop blowing up the world, that would be great.
    
    Const references bound to temporary objects extend the lifetime of the temporary object
    
    Temporary objects are normally destroyed at the end of the expression in which they are created.
    
    Given the statement `const int& ref { 5 };`, consider what would happen instead if the temporary object created to hold rvalue `5` was destroyed at the end of the expression that initializes `ref`. Reference `ref` would be left dangling (referencing an object that had been destroyed), and we’d get undefined behavior when we tried to access `ref`.
    
    To avoid dangling references in such cases, C++ has a special rule: When a const lvalue reference is *directly* bound to a temporary object, the lifetime of the temporary object is extended to match the lifetime of the reference.
    
    ```
    #include <iostream>
    
    int main()
    {
        const int& ref { 5 }; // The temporary object holding value 5 has its lifetime extended to match ref
    
        std::cout << ref << '\n'; // Therefore, we can safely use it here
    
        return 0;
    } // Both ref and the temporary object die here
    ```
    
    In the above example, when `ref` is initialized with rvalue `5`, a temporary object is created and `ref` is bound to that temporary object. The lifetime of the temporary object matches the lifetime of `ref`. Thus, we can safely print the value of `ref` in the next statement. Then both `ref` and the temporary object go out of scope and are destroyed at the end of the block.
    
    Key insight
    
    Lvalue references can only bind to modifiable lvalues.
    
    Lvalue references to const can bind to modifiable lvalues, non-modifiable lvalues, and rvalues. This makes them a much more flexible type of reference.
    
    For advanced readers
    
    Lifetime extension only works when a const reference is directly bound to a temporary. Temporaries returned from a function (even ones returned by const reference) are not eligible for lifetime extension.
    
    We show an example of this in lesson [12.12 -- Return by reference and return by address](https://www.learncpp.com/cpp-tutorial/return-by-reference-and-return-by-address/).
    
    For class type rvalues, binding a reference to a member will extend the lifetime of the entire object.
    
    So why does C++ allow a const reference to bind to an rvalue anyway? We’ll answer that question in the next lesson!
    
    Constexpr lvalue references Optional
    
    When applied to a reference, `constexpr` allows the reference to be used in a constant expression. Constexpr references have a particular limitation: they can only be bound to objects with static duration (either globals or static locals). This is because the compiler knows where static objects will be instantiated in memory, so it can treat that address as a compile-time constant.
    
    A constexpr reference cannot bind to a (non-static) local variable. This is because the address of local variables is not known until the function they are defined within is actually called.
    
    ```cpp
    int g_x { 5 };
    
    int main()
    {
        [[maybe_unused]] constexpr int& ref1 { g_x }; // ok, can bind to global
    
        static int s_x { 6 };
        [[maybe_unused]] constexpr int& ref2 { s_x }; // ok, can bind to static local
    
        int x { 6 };
        [[maybe_unused]] constexpr int& ref3 { x }; // compile error: can't bind to non-static object
    
        return 0;
    }
    ```
    
    When defining a constexpr reference to a const variable, we need to apply both `constexpr` (which applies to the reference) and `const` (which applies to the type being referenced).
    
    ```cpp
    int main()
    {
        static const int s_x { 6 }; // a const int
        [[maybe_unused]] constexpr const int& ref2 { s_x }; // needs both constexpr and const
    
        return 0;
    }
    ```
    
- **Pass by lvalue reference**
    
    https://www.learncpp.com/cpp-tutorial/pass-by-lvalue-reference/
    
    In the previous lessons, we introduced lvalue references ([12.3 -- Lvalue references](https://www.learncpp.com/cpp-tutorial/lvalue-references/)) and lvalue references to const ([12.4 -- Lvalue references to const](https://www.learncpp.com/cpp-tutorial/lvalue-references-to-const/)). In isolation, these may not have seemed very useful -- why create an alias to a variable when you can just use the variable itself?
    
    In this lesson, we’ll finally provide some insight into what makes references useful. And then starting later in this chapter, you’ll see references used regularly.
    
    First, some context. Back in lesson [2.4 -- Introduction to function parameters and arguments](https://www.learncpp.com/cpp-tutorial/introduction-to-function-parameters-and-arguments/) we discussed `pass by value`, where an argument passed to a function is copied into the function’s parameter:
    
    ```
    #include <iostream>
    
    void printValue(int y)
    {
        std::cout << y << '\n';
    } // y is destroyed here
    
    int main()
    {
        int x { 2 };
    
        printValue(x); // x is passed by value (copied) into parameter y (inexpensive)
    
        return 0;
    }
    ```
    
    In the above program, when `printValue(x)` is called, the value of `x` (`2`) is *copied* into parameter `y`. Then, at the end of the function, object `y` is destroyed.
    
    This means that when we called the function, we made a copy of our argument’s value, only to use it briefly and then destroy it! Fortunately, because fundamental types are cheap to copy, this isn’t a problem.
    
    Some objects are expensive to copy
    
    Most of the types provided by the standard library (such as `std::string`) are class types. Class types are usually expensive to copy. Whenever possible, we want to avoid making unnecessary copies of objects that are expensive to copy, especially when we will destroy those copies almost immediately.
    
    Consider the following program illustrating this point:
    
    ```
    #include <iostream>
    #include <string>
    
    void printValue(std::string y)
    {
        std::cout << y << '\n';
    } // y is destroyed here
    
    int main()
    {
        std::string x { "Hello, world!" }; // x is a std::string
    
        printValue(x); // x is passed by value (copied) into parameter y (expensive)
    
        return 0;
    }
    ```
    
    This prints
    
    ```
    Hello, world!
    
    ```
    
    While this program behaves like we expect, it’s also inefficient. Identically to the prior example, when `printValue()` is called, argument `x` is copied into `printValue()` parameter `y`. However, in this example, the argument is a `std::string` instead of an `int`, and `std::string` is a class type that is expensive to copy. And this expensive copy is made every time `printValue()` is called!
    
    We can do better.
    
    Pass by reference
    
    One way to avoid making an expensive copy of an argument when calling a function is to use `pass by reference` instead of `pass by value`. When using **pass by reference**, we declare a function parameter as a reference type (or const reference type) rather than as a normal type. When the function is called, each reference parameter is bound to the appropriate argument. Because the reference acts as an alias for the argument, no copy of the argument is made.
    
    Here’s the same example as above, using pass by reference instead of pass by value:
    
    ```
    #include <iostream>
    #include <string>
    
    void printValue(std::string& y) // type changed to std::string&
    {
        std::cout << y << '\n';
    } // y is destroyed here
    
    int main()
    {
        std::string x { "Hello, world!" };
    
        printValue(x); // x is now passed by reference into reference parameter y (inexpensive)
    
        return 0;
    }
    ```
    
    This program is identical to the prior one, except the type of parameter `y` has been changed from `std::string` to `std::string&` (an lvalue reference). Now, when `printValue(x)` is called, lvalue reference parameter `y` is bound to argument `x`. Binding a reference is always inexpensive, and no copy of `x` needs to be made. Because a reference acts as an alias for the object being referenced, when `printValue()` uses reference `y`, it’s accessing the actual argument `x` (rather than a copy of `x`).
    
    Key insight
    
    Pass by reference allows us to pass arguments to a function without making copies of those arguments each time the function is called.
    
    The following program demonstrates that a value parameter is a separate object from the argument, while a reference parameter is treated as if it were the argument:
    
    ```
    #include <iostream>
    
    void printAddresses(int val, int& ref)
    {
        std::cout << "The address of the value parameter is: " << &val << '\n';
        std::cout << "The address of the reference parameter is: " << &ref << '\n';
    }
    
    int main()
    {
        int x { 5 };
        std::cout << "The address of x is: " << &x << '\n';
        printAddresses(x, x);
    
        return 0;
    }
    ```
    
    One run of this program produced the following output:
    
    ```
    The address of x is: 0x7ffd16574de0
    The address of the value parameter is: 0x7ffd16574de4
    The address of the reference parameter is: 0x7ffd16574de0
    
    ```
    
    We can see that the argument has a different address than the value parameter, meaning the value parameter is a different object. Since they have separate memory addresses, in order for the value parameter to have the same value as the argument, the argument’s value must be copied into memory held by the value parameter.
    
    On the other hand, we can see that taking the address of the reference parameter yields an address identical to that of the argument. This means that the reference parameter is being treated as if it were the same object as the argument.
    
    Pass by reference allows us to change the value of an argument
    
    When an object is passed by value, the function parameter receives a copy of the argument. This means that any changes to the value of the parameter are made to the copy of the argument, not the argument itself:
    
    ```
    #include <iostream>
    
    void addOne(int y) // y is a copy of x
    {
        ++y; // this modifies the copy of x, not the actual object x
    }
    
    int main()
    {
        int x { 5 };
    
        std::cout << "value = " << x << '\n';
    
        addOne(x);
    
        std::cout << "value = " << x << '\n'; // x has not been modified
    
        return 0;
    }
    ```
    
    In the above program, because value parameter `y` is a copy of `x`, when we increment `y`, this only affects `y`. This program outputs:
    
    ```
    value = 5
    value = 5
    
    ```
    
    However, since a reference acts identically to the object being referenced, when using pass by reference, any changes made to the reference parameter *will* affect the argument:
    
    ```
    #include <iostream>
    
    void addOne(int& y) // y is bound to the actual object x
    {
        ++y; // this modifies the actual object x
    }
    
    int main()
    {
        int x { 5 };
    
        std::cout << "value = " << x << '\n';
    
        addOne(x);
    
        std::cout << "value = " << x << '\n'; // x has been modified
    
        return 0;
    }
    ```
    
    This program outputs:
    
    ```
    value = 5
    value = 6
    
    ```
    
    In the above example, `x` initially has value `5`. When `addOne(x)` is called, reference parameter `y` is bound to argument `x`. When the `addOne()` function increments reference `y`, it’s actually incrementing argument `x` from `5` to `6` (not a copy of `x`). This changed value persists even after `addOne()` has finished executing.
    
    Key insight
    
    Passing values by reference to non-const allows us to write functions that modify the value of arguments passed in.
    
    The ability for functions to modify the value of arguments passed in can be useful. Imagine you’ve written a function that determines whether a monster has successfully attacked the player. If so, the monster should do some amount of damage to the player’s health. If you pass your player object by reference, the function can directly modify the health of the actual player object that was passed in. If you pass the player object by value, you could only modify the health of a copy of the player object, which isn’t as useful.
    
    Pass by reference can only accept modifiable lvalue arguments
    
    Because a reference to a non-const value can only bind to a modifiable lvalue (essentially a non-const variable), this means that pass by reference only works with arguments that are modifiable lvalues. In practical terms, this significantly limits the usefulness of pass by reference to non-const, as it means we can not pass const variables or literals. For example:
    
    ```
    #include <iostream>
    
    void printValue(int& y) // y only accepts modifiable lvalues
    {
        std::cout << y << '\n';
    }
    
    int main()
    {
        int x { 5 };
        printValue(x); // ok: x is a modifiable lvalue
    
        const int z { 5 };
        printValue(z); // error: z is a non-modifiable lvalue
    
        printValue(5); // error: 5 is an rvalue
    
        return 0;
    }
    ```
    
    Fortunately, there’s an easy way around this, which we will discuss next lesson. We’ll also take a look at when to pass by value vs. pass by reference.
    
- **Pass by const lvalue reference**
    
    https://www.learncpp.com/cpp-tutorial/pass-by-const-lvalue-reference/
    
    Unlike a reference to non-const (which can only bind to modifiable lvalues), a reference to const can bind to modifiable lvalues, non-modifiable lvalues, and rvalues. Therefore, if we make a reference parameter const, then it will be able to bind to any type of argument:
    
    ```
    #include <iostream>
    
    void printRef(const int& y) // y is a const reference
    {
        std::cout << y << '\n';
    }
    
    int main()
    {
        int x { 5 };
        printRef(x);   // ok: x is a modifiable lvalue, y binds to x
    
        const int z { 5 };
        printRef(z);   // ok: z is a non-modifiable lvalue, y binds to z
    
        printRef(5);   // ok: 5 is rvalue literal, y binds to temporary int object
    
        return 0;
    }
    ```
    
    Passing by const reference offers the same primary benefit as pass by non-const reference (avoiding making a copy of the argument), while also guaranteeing that the function can *not* change the value being referenced.
    
    For example, the following is disallowed, because `ref` is const:
    
    ```
    void addOne(const int& ref)
    {
        ++ref; // not allowed: ref is const
    }
    ```
    
    In most cases, we don’t want our functions modifying the value of arguments.
    
    Best practice
    
    Favor passing by const reference over passing by non-const reference unless you have a specific reason to do otherwise (e.g. the function needs to change the value of an argument).
    
    Now we can understand the motivation for allowing const lvalue references to bind to rvalues: without that capability, there would be no way to pass literals (or other rvalues) to functions that used pass by reference!
    
    Passing arguments of a different type to a const lvalue reference parameter
    
    In lesson [12.4 -- Lvalue references to const](https://www.learncpp.com/cpp-tutorial/lvalue-references-to-const/), we noted that a const lvalue reference can bind to a value of a different type, as long as that value is convertible to the type of the reference. The conversion creates a temporary object that the reference parameter can then bind to.
    
    The primary motivation for allowing this is so that we can pass a value as an argument to either a value parameter or a const reference parameter in exactly the same way:
    
    ```
    #include <iostream>
    
    void printVal(double d)
    {
        std::cout << d << '\n';
    }
    
    void printRef(const double& d)
    {
        std::cout << d << '\n';
    }
    
    int main()
    {
        printVal(5); // 5 converted to temporary double, copied to parameter d
        printRef(5); // 5 converted to temporary double, bound to parameter d
    
        return 0;
    }
    ```
    
    With pass-by-value, we expect a copy to be made, so if a conversion occurs first (resulting in an additional copy) it’s rarely an issue (and the compiler will likely optimize one of the two copies away).
    
    However, we often use pass by reference when we *don’t* want a copy to be made. If a conversion occurs first, this will typically result in a (possibly expensive) copy being made, which can be suboptimal.
    
    Warning
    
    With pass by reference, ensure the type of the argument matches the type of the reference, or it will result in an unexpected (and possibly expensive) conversion.
    
    Mixing pass by value and pass by reference
    
    A function with multiple parameters can determine whether each parameter is passed by value or passed by reference individually.
    
    For example:
    
    ```
    #include <string>
    
    void foo(int a, int& b, const std::string& c)
    {
    }
    
    int main()
    {
        int x { 5 };
        const std::string s { "Hello, world!" };
    
        foo(5, x, s);
    
        return 0;
    }
    ```
    
    In the above example, the first argument is passed by value, the second by reference, and the third by const reference.
    
    When to use pass by value vs pass by reference
    
    For most C++ beginners, the choice of whether to use pass by value or pass by reference isn’t very obvious. Fortunately, there’s a straightforward rule of thumb that will serve you well in the majority cases.
    
    - Fundamental types and enumerated types are cheap to copy, so they are typically passed by value.
    - Class types can be expensive to copy (sometimes significantly so), so they are typically passed by const reference.
    
    Best practice
    
    As a rule of thumb, pass fundamental types by value and class types by const reference.
    
    If you aren’t sure what to do, pass by const reference, as you’re less likely to encounter unexpected behavior.
    
    Tip
    
    Here’s a partial list of other interesting cases:
    
    The following are often passed by value (because it is more efficient):
    
    - Enumerated types (unscoped and scoped enumerations).
    - Views and spans (e.g. `std::string_view`, `std::span`).
    - Types that mimic references or (non-owning) pointers (e.g. iterators, `std::reference_wrapper`).
    - Cheap-to-copy class types that have value semantics (e.g. `std::pair` with elements of fundamental types, `std::optional`, `std::expected`).
    
    Pass by reference should be used for the following:
    
    - Arguments that need to be modified by the function.
    - Types that aren’t copyable (such as `std::ostream`).
    - Types where copying has ownership implications that we want to avoid (e.g. `std::unique_ptr`, `std::shared_ptr`).
    - Types that have virtual functions or are likely to be inherited from (due to object slicing concerns, covered in lesson [25.9 -- Object slicing](https://www.learncpp.com/cpp-tutorial/object-slicing/)).
    
    The cost of pass by value vs pass by reference Advanced
    
    Not all class types need to be passed by reference (such as `std::string_view`, which is normally passed by value). And you may be wondering why we don’t just pass everything by reference. In this section (which is optional reading), we discuss the cost of pass by value vs pass by reference, and refine our best practice as to when we should use each.
    
    First, we need to consider the cost of initializing the function parameter. With pass by value, initialization means making a copy. The cost of copying an object is generally proportional to two things:
    
    - The size of the object. Objects that use more memory take more time to copy.
    - Any additional setup costs. Some class types do additional setup when they are instantiated (e.g. such as opening a file or database, or allocating a certain amount of dynamic memory to hold an object of a variable size). These setup costs must be paid each time an object is copied.
    
    On the other hand, binding a reference to an object is always fast (about the same speed as copying a fundamental type).
    
    Second, we need to consider the cost of using the function parameter. When setting up a function call, the compiler may be able to optimize by placing a reference or copy of a passed-by-value argument (if it is small in size) into a CPU register (which is fast to access) rather than into RAM (which is slower to access).
    
    Each time a value parameter is used, the running program can directly access the storage location (CPU register or RAM) of the copied argument. However, when a reference parameter is used, there is usually an extra step. The running program must first directly access the storage location (CPU register or RAM) allocated to the reference, in order to determine which object is being referenced. Only then can it access the storage location of the referenced object (in RAM).
    
    Therefore, each use of a value parameter is a single CPU register or RAM access, whereas each use of a reference parameter is a single CPU register or RAM access plus a second RAM access.
    
    Third, the compiler can sometimes optimize code that uses pass by value more effectively than code that uses pass by reference. In particular, optimizers have to be conservative when there is any chance of **aliasing** (when two or more pointers or references can access the same object). Because pass by value results in the copy of argument values, there is no chance for aliasing to occur, allowing optimizers to be more aggressive.
    
    We can now answer these question of why we don’t pass everything by reference:
    
    - For objects that are cheap to copy, the cost of copying is similar to the cost of binding, but accessing the objects is faster and the compiler is likely to be able to optimize better.
    - For objects that are expensive to copy, the cost of the copy dominates other performance considerations.
    
    The last question then is, how do we define “cheap to copy”? There is no absolute answer here, as this varies by compiler, use case, and architecture. However, we can formulate a good rule of thumb: An object is cheap to copy if it uses 2 or fewer “words” of memory (where a “word” is approximated by the size of a memory address) and it has no setup costs.
    
    The following program defines a function-like macro that can be used to determine if a type (or object) is cheap to copy accordingly:
    
    ```
    #include <iostream>
    
    // Function-like macro that evaluates to true if the type (or object) is equal to or smaller than
    // the size of two memory addresses
    #define isSmall(T) (sizeof(T) <= 2 * sizeof(void*))
    
    struct S
    {
        double a;
        double b;
        double c;
    };
    
    int main()
    {
        std::cout << std::boolalpha; // print true or false rather than 1 or 0
        std::cout << isSmall(int) << '\n'; // true
    
        double d {};
        std::cout << isSmall(d) << '\n'; // true
        std::cout << isSmall(S) << '\n'; // false
    
        return 0;
    }
    ```
    
    As an aside…
    
    We use a preprocessor function-like macro here so that we can provide either an object OR a type name as a parameter (as C++ functions disallow passing types as a parameter).
    
    However, it can be hard to know whether a class type object has setup costs or not. It’s best to assume that most standard library classes have setup costs, unless you know otherwise that they don’t.
    
    Tip
    
    An object of type T is cheap to copy if `sizeof(T) <= 2 * sizeof(void*)` and has no additional setup costs.
    
    For function parameters, prefer `std::string_view` over `const std::string&` in most cases
    
    One question that comes up often in modern C++: when writing a function that has a string parameter, should the type of the parameter be `const std::string&` or `std::string_view`?
    
    In most cases, `std::string_view` is the better choice, as it can handle a wider range of argument types efficiently. A `std::string_view` parameter also allows the caller to pass in a substring without having to copy that substring into its own string first.
    
    ```
    void doSomething(const std::string&);
    void doSomething(std::string_view);   // prefer this in most cases
    ```
    
    There are a few cases where using a `const std::string&` parameter may be more appropriate:
    
    - If you’re using C++14 or older, `std::string_view` isn’t available.
    - If your function needs to call some other function that takes a C-style string or `std::string` parameter, then `const std::string&` may be a better choice, as `std::string_view` is not guaranteed to be null-terminated (something that C-style string functions expect) and does not efficiently convert back to a `std::string`.
    
    Best practice
    
    Prefer passing strings using `std::string_view` (by value) instead of `const std::string&`, unless your function calls other functions that require C-style strings or `std::string` parameters.
    
    Why `std::string_view` parameters are more efficient than `const std::string&` Advanced
    
    In C++, a string argument will typically be a `std::string`, a `std::string_view`, or a C-style string/string literal.
    
    As reminders:
    
    - If the type of an argument does not match the type of the corresponding parameter, the compiler will try to implicitly convert the argument to match the type of the parameter.
    - Converting a value creates a temporary object of the converted type.
    - Creating (or copying) a `std::string_view` is inexpensive, as `std::string_view` does not make a copy of the string it is viewing.
    - Creating (or copying) a `std::string` can be expensive, as each `std::string` object makes a copy of the string.
    
    Here’s a table showing what happens when we try to pass each type:
    
    | Argument Type | std::string_view parameter | const std::string& parameter |
    | --- | --- | --- |
    | std::string | Inexpensive conversion | Inexpensive reference binding |
    | std::string_view | Inexpensive copy | Expensive explicit conversion to `std::string` |
    | C-style string / literal | Inexpensive conversion | Expensive conversion |
    
    With a `std::string_view` value parameter:
    
    - If we pass in a `std::string` argument, the compiler will convert the `std::string` to a `std::string_view`, which is inexpensive, so this is fine.
    - If we pass in a `std::string_view` argument, the compiler will copy the argument into the parameter, which is inexpensive, so this is fine.
    - If we pass in a C-style string or string literal, the compiler will convert these to a `std::string_view`, which is inexpensive, so this is fine.
    
    As you can see, `std::string_view` handles all three cases inexpensively.
    
    With a `const std::string&` reference parameter:
    
    - If we pass in a `std::string` argument, the parameter will reference bind to the argument, which is inexpensive, so this is fine.
    - If we pass in a `std::string_view` argument, the compiler will refuse to do an implicit conversion, and produce a compilation error. We can use `static_cast` to do an explicit conversion (to `std::string`), but this conversion is expensive (since `std::string` will make a copy of the string being viewed). Once the conversion is done, the parameter will reference bind to the result, which is inexpensive. But we’ve made an expensive copy to do the conversion, so this isn’t great.
    - If we pass in a C-style string or string literal, the compiler will implicitly convert this to a `std::string`, which is expensive. So this isn’t great either.
    
    Thus, a `const std::string&` parameter only handles `std::string` arguments inexpensively.
    
    The same, in code form:
    
    ```
    #include <iostream>
    #include <string>
    #include <string_view>
    
    void printSV(std::string_view sv)
    {
        std::cout << sv << '\n';
    }
    
    void printS(const std::string& s)
    {
        std::cout << s << '\n';
    }
    
    int main()
    {
        std::string s{ "Hello, world" };
        std::string_view sv { s };
    
        // Pass to `std::string_view` parameter
        printSV(s);              // ok: inexpensive conversion from std::string to std::string_view
        printSV(sv);             // ok: inexpensive copy of std::string_view
        printSV("Hello, world"); // ok: inexpensive conversion of C-style string literal to std::string_view
    
        // pass to `const std::string&` parameter
        printS(s);              // ok: inexpensive bind to std::string argument
        printS(sv);             // compile error: cannot implicit convert std::string_view to std::string
        printS(static_cast<std::string>(sv)); // bad: expensive creation of std::string temporary
        printS("Hello, world"); // bad: expensive creation of std::string temporary
    
        return 0;
    }
    ```
    
    Additionally, we need to consider the cost of accessing the parameter inside the function. Because a `std::string_view` parameter is a normal object, the string being viewed can be accessed directly. Accessing a `std::string&` parameter requires an additional step to get to the referenced object before the string can be accessed.
    
    Finally, if we want to pass in a substring of an existing string (of any type), it is comparatively cheap to create a `std::string_view` substring, which can then be cheaply passed to a `std::string_view` parameter. In comparison, passing a substring to a `const std::string&` is more expensive, as the substring must at some point be copied into the `std::string` that the reference parameter binds to.
