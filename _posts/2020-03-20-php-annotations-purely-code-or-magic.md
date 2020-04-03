---
layout:     post
title:      PHP Annotations - purely code or magic?
author:     Joel Medeiros
date:       2020-03-28
summary:    
categories: php
thumbnail:  
tags:
 - php
 - annotations
---

As developers we seek for the best and most productive way to write code. To help us, we use a bunch of tools, components, libraries and packages, and not always we try to understand what happens deep in the third party code though.  
We can easily run commands like `composer require`, `brew install` or `npm install` to get libraries and frameworks ready to use, we do not need to worry about how the functionally is abstracted from some component either which implementations it follows. With that, this article seeks to understand and desmistify a functionality that is widely used and abstracted from libraries and frameworks but very criticized by the community, the "Magic Annotations".
  
# First of all, what is it?

The definition I bring to computing is:

>Something  that  describes  an  aspect  of  the  subject  through  metadata  in runtime. In other  words, the  annotation  effects  on a class, method  or  object are only  when  it is  executed.  
>— Based on ["Annotations in PHP: They Exist" - Rafael Dohms](https://www.slideshare.net/rdohms/annotations-in-php-they-exist/){:target="_blank"}

Annotations are also called **decorators** or **attributes** by languages such as Python, JavaScript, C# and Rust, nowadays in PHP they're still called **annotations**.
Read it until the end to understand why Annotations are **metadata** not comments unlike many people think.

# What is it used for?
In the beginning, XML was extensively used for metadata and communication between machines for web applications, in the early century, developers sought an easy way to write and read metadata in the code, not only to set configurations, but also, transform metadata in objects and parameters. And after that, this feature started appearing in programming languages, such as:
- Java had the first discussion of *A Metadata Facility for the Java Programming Language* started in 2002 with [JSR-175](https://jcp.org/en/jsr/detail?id=175){:target="_blank"} and that was approved and implemented in 2004;
- Python in 2003 had the first discussion about *Decorators for functions* with [PEP-318](https://www.python.org/dev/peps/pep-0318/){:target="_blank"}, that was approved and implemented in 2004;
- Rust had *Attributes modeled as Attributes* in 2012 following [ECMA-335](https://www.ecma-international.org/publications/standards/Ecma-335.htm){:target="_blank"};
- C# with .NET core 1.1 added attribute interface in 2016;

Nowadays it is used for some things, like:
- Configuration
- Code documenting
- Change an object behavior in runtime
  
# How does it work?
To use annotations, languages need to have an *engine* running "under the table" to interpret natural language and transform it into computing instructions. For example, JAVA language has JAPE (Java Annotation Patterns Engine), this engine is based on regular expressions, it consists of a set of phases, each of which consists of a set of patterns/actions that "translates" natural language into language instructions, such as deprecate a method, override a class, suppress warnings and so on, this process is called *tokenization*.

Here is a flowchart that represents this process:

<figure style="text-align: center">
  <a href="/assets/images/posts/php-annotations-purely-code-or-magic/flowchart-annotation-engine.jpeg" target="_blank">
    <img src="/assets/images/posts/php-annotations-purely-code-or-magic/flowchart-annotation-engine.jpeg" alt="How an annotation engine works" />
  </a>
  <figcaption>How an annotation engine works</figcaption>
</figure>

Different than other languages, PHP does not have a **native engine** to read annotations.

PHP already had a lot of discussions to include a native engine for annotations in its core, but the community declined believing this was not necessary, since 2010 there are opened Request For Comments (RFC) with this subject:
- [Class Metadata (2010)](https://wiki.php.net/rfc/annotations){:target="_blank"} - Declined
- [Annotations in DocBlock (2010)](https://wiki.php.net/rfc/annotations-in-docblock){:target="_blank"} - Inactive
- [Reflection Annotations using the Doc-Comment (2013)](https://wiki.php.net/rfc/reflection_doccomment_annotations){:target="_blank"} - Under Discussion
- [Attributes (2016)](https://wiki.php.net/rfc/attributes){:target="_blank"} - Declined
- [Simple annotations (2016)](https://wiki.php.net/rfc/simple-annotations){:target="_blank"} - Draft

Now you know PHP doesn't have Annotations, Decorators, Attributes or something capable to easily provide an interface to read and write metadata, it's the end of this article ...

Nah, I'm just kidding ...

Whoever thinks PHP does not have a way to manipulate metadata by the fact that it does not have a native engine is wrong, to extract metadata this "magic" is done through Reflections and parsers.

# What is Reflection?
> In computer science, reflection is the ability of a process to examine, introspect, and modify its own structure and behavior. 
> — [Wikipedia](https://en.wikipedia.org/wiki/Reflection_(computer_programming)){:target="_blank"}

Many languages that have Reflections feature in their core have three main ways to use it: type introspection, dynamic invocation and metadata viewing; The last one is  an important topic to understand how annotations work inside PHP.
A lot of information, right? Let me simplify it to be more clear:

## Type Introspection
The ability for a program to examine itself, for example, when you need to statically validate a variable or object type:

PHP
```php
public function test($a): void {
    if ($a instanceof MyClass) {
        // do something
    } 
}
```

Java
```java
public void test(Object a) {
    if (a instanceof MyClass) {
        // do something
    }
}
```

C#
```csharp
public void test(Object a) {
    if (a is MyClass)
    {
        // do something
    }
}
```

## Dynamic invocation
The process for accessing dynamically properties, methods or class from an object changing its behavior in runtime:

Java
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

PHP
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

## Metadata viewing
It is the capacity to read metadata from any kind of document such as classes, parameters types, attributes, methods and their parameters. For PHP language, it is the most important thing that you have to know when we are talking about annotations, it is used to read metadata from `docblocks`:

PHP
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

# How does Annotations work in PHP?

Now you know the three main types of Reflections (I hope you have read them), in PHP, **metadata viewing** is used to reproduce Annotations feature through applying parsers based on regular expressions to transform natural language into computing language, in other words, this process transforms metadata into variables, properties or classes. Let us see a simple example of applying regular expressions to extract metadata:

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
> **NOTE**: Each index of `$match` array has a piece of an annotation, each one of them is called **token**, that could represent a method, configuration or documentation to be called. This process to extract information from metadata and normalize them is called **tokenization**.

You may be thinking: "what a hard work to have that!".
Don't worry, PHP has a great community that has already created a bunch of libraries to do that for us, here are some of them:

[Doctrine Annotations](https://github.com/doctrine/annotations){:target="_blank"}

[PHP Documentor](https://github.com/phpDocumentor/phpDocumentor){:target="_blank"}

[PHP Annotations](https://github.com/php-annotations){:target="_blank"}

# It’s only comments, right?

Not exactly, going deep in PHP Core we see two comment types, when we write inline comments or slash with single asterisk multiline comments(`/* ... */`), PHP understands it as `T_COMMENT` notations, it is ignored by PHP cache engine (`opcache`), otherwise, when we write slash with two asterisks multiline comments (`/** ... */`) it is understood as `T_DOC_COMMENT` which it is stored in PHP cache layer. 

**T_COMMENT**

```
// It is a comment
/* It is a comment */
# It is a comment
/*
 * It still is a comment
 */
```

**T_DOC_COMMENT**

```
/**
 * It is a doc comment
 */
```

This is what makes people confused about annotations in PHP, a single character could change a feature making hard to find bugs or cause a hard understanding for beginners or inexperienced developers. 

# Who does use annotations?

[PHPUnit](https://github.com/sebastianbergmann/phpunit){:target="_blank"}
```php
use PHPUnit\Framework\TestCase;

/**
 * @backupGlobals disabled
 */
class MyTest extends TestCase
{
    /**
     * @backupGlobals enabled
     */
    public function testThatInteractsWithGlobalVariables()
    {
        // ...
    }
}
```

[Doctrine](https://github.com/doctrine/){:target="_blank"}
```php
/** @Entity */
class Message
{
    /** @Column(type="integer") */
    private $id;
    /** @Column(length=140) */
    private $text;
    /** @Column(type="datetime", name="posted_at") */
    private $postedAt;
}
```

[Symfony](https://github.com/symfony){:target="_blank"}
```php
// src/Controller/BlogController.php
namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;

class BlogController extends AbstractController
{
    /**
     * @Route("/blog", name="blog_list")
     */
    public function list()
    {
        // ...
    }
}
```

Now that you understand what is annotation, how it works and who uses it, you may question:

# Why or not use annotations?

Pros

- It does not affects software semantic, in other words, you can inject behaviors in an object without having to extend, implement or create a new object instance. 
- It has performance gain in comparison of file reading to set configs, like we saw before, everything is read and stored in PHP cache which brings a considerable runtime performance gain.
- It is easy to read code, noticing or not, you write docs inside docblocks, in addition, it gives you the possibility to use reading docblock tools (like [PHP Documentor](https://github.com/phpDocumentor/phpDocumentor){:target="_blank"}) to generate consistent application documents.
- It helps code refactoring process segregating static data and it helps to achieve some clean code practices. 

Cons

- It is hard/impossible to test, due to the fact that we test an object that is using annotations we can not test annotations itself during an execution time.
- It is hard/impossible to debug, due to the fact that we have a runtime behavior injected, sometimes we can not back trace how that object has changed.
- Changing comments should not change software operation, like we said before, it is too confusing to use slash with two asterisks (`/** .... */`) as comments to set/change behaviors.
> Thinking about it, [in the latest PHP RFC related to Annotations (2020)](https://wiki.php.net/rfc/attributes_v2){:target="_blank"}, where annotations are called as **attributes** by Benjamin Everlei, he proposes the use of `<<..>>` instead of `/** ... */`


# Conclusion

Annotations could be useful, you know as Abraham Maslow said "I suppose it is tempting, if the only tool you have is a hammer, to treat everything as if it were a nail." ([Wikipedia](https://en.wikipedia.org/wiki/Law_of_the_instrument)), you will have to know the right moment and the right place to use this feature otherwise you will have all problems listed in cons section. The important thing here, you understand how it works behind scenes and what impact it will cause in your code. 

If you are looking for some use cases you can check in [my github](https://github.com/joelmedeiros/useful-annotations/){:target="_blank"} where I used doctrine annotations as "engine". 

I hope it clarified your understanding of annotations and how PHP works with that.