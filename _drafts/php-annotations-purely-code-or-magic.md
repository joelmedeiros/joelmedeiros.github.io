---
layout:     post
title:      PHP Annotations - purely code or magic?
author:     Joel Medeiros
summary:    
categories: php
thumbnail:  
tags:
 - php
 - annotations
---

# PHP Annotations - Purely code or margic?  
  
### TL;DR  
As developers we seek for the best and most productive way to write code. To help us, we use a bunch of tools, components, libraries and packages, and not always we try to understand what happens deep in the third party code though.  
Easily we can run commands like `composer require`, `brew install` or `npm install` to get libraries and frameworks ready to use, we do not need to worry about how the functionally is abstracted from some component either which implementations it follows, with that, this article seeks to understand and desmistify a functionality that is widely used and abstracted from libraries and frameworks but very criticized by the community, the "Magic Annotations".    
  
### First of all, what is it?  
  
The definition I bring to computing is:   
  
>"Something  that  describes  an  aspect  of  the  subject  through  metadata  in runtime. In other  words, the  annotation  effects  on a class, method  or  object are only  when  it is  executed".  — Based on ["Annotations in PHP: They Exist" - Rafael Dohms](https://www.slideshare.net/rdohms/annotations-in-php-they-exist/)  
  
Annotations are also called as `decorators` or `attributes` by languages such as Python, JavaScript, C# and Rust, nowadays in PHP is still called as `annotations`.  
Read it until the end to understand why Annotations are **metadata** not comments unlike many think.

### For what is it used?  
In the beginning, XML was extensively used for metadata and communication between machines for web applications, in the beginning of the century, developers sought an easy way to write and read metadata in the code, not only to set configurations, but also, transform metadata in objects and parameters. And after that, this feature started appearing in programming languages, such as:  
- Java had the first discussion of `A Metadata Facility for the Java Programming Language` started in 2002 with [JSR-175](https://jcp.org/en/jsr/detail?id=175) and that was approved and implemented in 2004;  
- Python in 2003 had the first discussion about `Decorators for functions` with [PEP 318](https://www.python.org/dev/peps/pep-0318/), that was approved and implemented in 2004;  
- Rust had `Attributes modeled as Attributes` in 2012 following [ECMA-335](https://www.ecma-international.org/publications/standards/Ecma-335.htm);  
- C# with .NET core 1.1 added attribute interface in 2016;

Nowadays is used for some things:  
- Configuration  
- Code documenting  
- Change a object behavior in runtime  
  
### How does it work?   
To use annotations, languages need to have an *engine* running "under the table" to interpret natural language and transform it into computing instructions. For example, JAVA language has JAPE (Java Annotation Patterns Engine), this engine is based on regular expressions, and consists of a set of phases, each of which consists of a set of pattern/action that "translates" natural language into language instructions, such as deprecate a method, override a class, suppress warnings and so on, this process is called "tokenization".
Different of other languages, PHP does not have a **native engine** to read annotations.
PHP already had a lots of discussions to include a native engine for annotations in its core, but the community declined believing this is not necessary, since 2010 there are opened Request For Comments (RFC) with this subject:
- [Class Metadata (2010)](https://wiki.php.net/rfc/annotations) - Declined
- [Annotations in DocBlock (2010)](https://wiki.php.net/rfc/annotations-in-docblock) - Inactive
- [Reflection Annotations using the Doc-Comment (2013)](https://wiki.php.net/rfc/reflection_doccomment_annotations) - Under Discussion
- [Attributes (2016)](https://wiki.php.net/rfc/attributes) - Declined
- [Simple annotations (2016)](https://wiki.php.net/rfc/simple-annotations) - Draft

Now you know PHP doesn't have Annotations, Decorators, Attributes or something capable to easily provide an interface to read and write metadata, it's the end of this article ...

Nah, I'm just kidding ...

Whoever who thinks PHP does not have a way to manipulate metadata by the fact that it does not have a native engine is wrong, to extract metadata this "magic" is done through Reflections and parsers.

### What is Reflection?
> "In computer science, reflection is the ability of a process to examine, introspect, and modify its own structure and behavior". — [Wikipedia](https://en.wikipedia.org/wiki/Reflection_(computer_programming))

Many languages that have Reflections feature in their core have three main ways to use: type introspection, dynamic invocation and metadata viewing; That last one is  an important topic to understand how annotations work inside PHP. 
A lots of information, right? Let me simplify to be more clear:

#### Type Introspection
The ability for a program to examine itself, for example, when you need to statically validate a variable or object type:

```php
public function test($a): void {
    if ($a instanceof MyClass) {
        // do something
    } 
}
```

```java
public void test(Object a) {
    if (a instanceof MyClass) {
        // do something
    }
}
```

```csharp
public void test(Object a) {
    if (a is MyClass)
    {
        // do something
    }
}
```

#### Dynamic invocation
The process for accessing dynamically properties, methods or class from an object changing its behavior in runtime:

```java
public class MyClass {
    private String a = "Some value";
}

Class<?> myClass = MyClass.class;
Object reflectionClass = myClass.newInstance();

Field reflectionProperty = reflectionClass.getClass().
            getDeclaredField("a");

reflectionProperty.setAccessible(true);
reflectionProperty.set(MyClass, "My modified parameter")

System.out.println((String) reflectionProperty.get(reflectionClass));
// My modified parameter
```

```php
class MyClass {
    private $a = 'Some value';

    /**
     * @return string
     */
    public function getA(): string {
        return $this->a;
    }
}

$myClass = new MyClass();
$reflectionProperty = new ReflectionProperty(MyClass::class, 'a');

/* It changes MyClass behavior in runtime */
$reflectionProperty->setAccessible(true);
$reflectionProperty->setValue($myClass, 'My modified parameter');
 
var_dump($myClass->getA()); 
// string(21) "My modified parameter"
```

#### Metadata viewing
It is a capacity to read metadata from any kind of document such as classes, parameters types, attributes, methods and their parameters. For PHP language, it is the most important thing that you have to know when we are talking about annotations, it is used to read metadata from `docblocks`:

```php
/** 
* A class with metadata
*
* @param  foo bar
* @return baz
*/
class myClass{}

$reflectionClass = new ReflectionClass('myClass');
var_dump($reflectionClass->getDocComment());
```
It will reproduce:
```
string(55) "/** 
* A test class
*
* @param  foo bar
* @return baz
*/"
```

### How does Annotations work in PHP?

Now you know the three main types of Reflections (I hope you have read them), in PHP, **metadata viewing** is used to reproduce Annotations feature through applying parsers based on regular expressions to transform natural language into computing language, in other words, this process transforms metadata into variables, properties or classes. Let us see an simple example of applying regular expressions to extract metadata: 

```php
/** 
* A test class  
*  
* @param  foo bar  
* @return baz  
*/  
class TestClass{}  
  
$reflectionClass = new ReflectionClass('TestClass');
$reflectionClassDocBlock = $reflectionClass->getDocComment();

preg_match_all(
    "#(@[a-zA-Z]+\s*[a-zA-Z0-9, ()_].*)#",
    $reflectionClassDocBlock,
    $matches,
    PREG_PATTERN_ORDER
);

var_dump($matches);
```

```
array(2) {
  [0]=>
  array(2) {
    [0]=>
    string(15) "@param foo bar
"
    [1]=>
    string(12) "@return baz
"
  }
  [1]=>
  array(2) {
    [0]=>
    string(15) "@param foo bar
"
    [1]=>
    string(12) "@return baz
"
  }
}
```
> **NOTE**: Each index of `$match` array has a piece of an annotation, each one of them is called `token`, that could represent a method, configuration or documentation to be called. This process to extract information from metadata and normalize them is called **tokenization**.

You could be thinking, "what a hard work to have that", don't worry PHP has a great community that have already made a bunch of libraries to do that for us, here are some:
  
[Doctrine Annotations](https://github.com/doctrine/annotations)  
  
[PHP Documentor](https://github.com/phpDocumentor/phpDocumentor)  
  
[PHP Annotations](https://github.com/php-annotations)