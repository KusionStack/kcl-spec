# Preview

A KCL program consists of one or more `.k` files. The followings are a few examples to give a brief introduction to KCL before details are elaborated.

## Hello, World

Although being a configuration language, KCL can also be used to implement a widely known **Hello World** program.

The following is the code of the program. Suppose it is saved in a file named `hello.k`.

> **note**
>
> Note that each KCL file should have a `.k` suffix, and this is mandatory. When the operating system is capital sensitive, the suffix must use the little capital character `k`.

```python
print("Hello, World!")
```

A KCL program can be executed with a KCL execution engine, which is also known as a KCL Virtual Machine (VM). The execution of this program will print the following message to the standard output:

```
Hello, World!
```

The file `hello.k` has only one statement. This statement invokes a function `print` and passes a string `"Hello, World!"` to the function. The `print` function then prints the string to the standard output.

## Simple YAML Generation

The purpose of KCL is to write and organize configurations that are traditionally represented with YAML and other forms such as JSON and XML as a data format.

Let's take an example to demonstrate how to generate YAML from a KCL program.

The following program (`simple.k`) has a number of variables named `spam`, `ham` and `eggs` defined.

```python
spam = 123

ham = ["foo", "bar"]

eggs = {
    one = 1
    two = 2
}
```

After execution, the virtual machine will print the following contents to the standard output:

```yaml
spam: 123
ham:
- foo
- bar
eggs:
  one: 1
  two: 2
```

The printed contents follow the YAML grammar. A KCL virtual machine should offer options to print the contents into a file, but this is not within the scope of this specification.

The variable `spam` has a scalar value, which is an integer. The variable `ham` saves a list with two elements, each being a string. The variable `eggs` saves a so-called dictionary (or dict, for short). The dictionary has two elements. Each element consists of a key and a value. The keys of these elements are strings `one` and `two`, and the values are integers `1` and `2` respectively.

All variables (except some exceptions that will be discussed in later chapters) defined in a KCL program are dumped according to the YAML grammar, after the execution of the virtual machine.

## Variables are Constants

In KCL, variables are in fact constants. In other words, once assigned, a variable cannot hold any other value.

However, for convenience, we call a **constant variable** a variable, but you should be noted that unless for some exceptions, all variables are actually constant variables.

For example, the following file `modify_variable.k` will cause an error if being executed in a virtual machine.

```python
spam = 123

spam = ["foo", "bar"]
```

In general, the data type in KCL has no concept and support of in-place modification, so that a modification in KCL is an expression in which a new one is created in the declaration manner.

## Schema

The heart of KCL is a syntax called the **schema** syntax. The following is an example:

```python
schema person:
    firstName: str
    lastName: str
    age: int = 0
    check:
        0 <= age < 200

JohnDoe = person {
    firstName = "John",
    lastName = "Doe",
    age = 25
}

YoungBaby = person {
    firstName = "Young",
    lastName = "Baby"
}

# OldMan = person {
#     firstName = "Old",
#     lastName = "Man",
#     age = 500
# }

# WrongDefinition = person {
#     FirstName = "Wrong",
#     lastName = "Right"
# }
```

In the file, we define a schema named `person`. A schema describes a structured data type saved in a dictionary. A person has a first name and a last name, therefore there are these elements in the schema. The syntax is `element_name type`. In the example, these elements are of type string (`str`).

A person also have an age, which is an integer (`int`). Here we want to give the age a default value `0`. The syntax is `element_name type = default_value`.

A schema can have a so-called check block. A check block starts with `check:`. The texts in the next indentation level forms the contents of the check block. In this example, the contents are `age >= 0 and age < 200`, which is a self-explainary expression that is `True` only when the age is greater than or equal to `0` and less than `200`. (`200` is already too old for a person, isn't it?). In addition, more complex logical judgments can be defined through KCL check expressions. See more details in the **schema** spec.

After defining the schema, let's create a structured data (i.e., a dict) named `JohnDoe` using the schema.

If you are acquainted to Python, you will immediately notice that by removing the token `person`, the definition is instantly turned into a dict definition. By adding the token `person`, an ordinary dict definition statement is converted into a dict definition statement using the schema syntax. That means more processing will be undertaken. One thing the virtual machine does is to use the check block in the schema definition to validate the newly defined `JohnDoe`. Since the age does not go out of the scope, the check is successful.

After the definition, `JohnDoe` becomes a variable that has a dict value with three elements: `"firstName"`, `"lastName"` and `"age"`.

### Default Value

A dict definition using schema can do more. After defining `JohnDoe`, we define another variable `YoungBaby`. In the definition, no age is specified. Since in the schema, a default value `0` is given to the element `age`, an element with the key `"age"` is created in the generated dict, and its value is `0`.

### Validation Failure

After defining `YoungBaby`, we want to define a variable `OldMan`, whose
age is `500`. Since the expression `0 <= age < 200` is evaluated to
`False`, an error will be reported and the execution of the KCL program
would fail.

> **note**
>
> We have to comment the code out. The comment syntax in KCL is derived
> from Python. The `#` character is used to denote that the remainder
> characters in this physical line are treated as comments.

### Other Things a Schema Can Do

A schema can do more. When defining a variable `WrongDefinition`, the first element's key is miss spelled into `"FirstName"`.

> **note**
>
> KCL is capital sensitive, therefore it DOES NOT think `"FirstName"` is equivalent to `"firstName"`.

Two errors can happen when executing the program, if the related code is uncommented.

- The first error is that the element with the key `"firstName"` is not defined. Since this element does not have a default value, it must be defined when using the schema syntax to define a dict.
- The second error is that an element with the key `"FirstName"` is defined, but it is not specified in the schema definition.

Since the virtual machine can end its execution when one error happens, which of these two errors is reported is up to the implementation of the virtual machine.

> **note**
>
> There is a way to allow a schema to have additional elements than what are specified. This approach will be discussed in later chapters.

In addition, you can write complex statements in the schema context and define complex structures by combination and inheritance. See more details in the **schema** spec.
