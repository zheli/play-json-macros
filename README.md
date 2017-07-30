# Play JSON macros

This library provides helpful macros for [Play JSON](https://github.com/playframework/play-json).

- `@json` adds a JSON formatter for a case class. 
- `@jsonUnwrap` and `JsonMacros.unwrapFormat` creates a JSON formatter which "unwraps" the field from a case class with a single field. 

Inspired by [json-annotation](https://github.com/vital-software/json-annotation).

## Setup

```scala
addCompilerPlugin("org.scalamacros" % "paradise" % "2.1.0" cross CrossVersion.full)
```

This library is compiled for both Scala 2.11 and 2.12.

## Documentation

This library adds a number of annotations for automatically creating JSON formatters. This is how they work:

By using a `@json` (or any of the other annotations) a JSON formatter will automatically be created in the companion object.

This code:
```scala
@json case class Person(name: String, age: Int)
```
Will be transformed into this: 
```scala
case class Person(name: String, age: Int)

object Person {
  implicit val format = Json.format[Person]
}
```

### `@json`

```scala
@json case class Person(name: String, age: Int = 5)

Json.toJson(Person("Olle")) == Json.obj("name" -> "Olle", "age" -> 5)
```

```scala
@json(defaultValues = false) case class Person(name: String, age: Int)

Json.toJson(Person("Olle", 5)) == Json.obj("name" -> "Olle", "age" -> 5)

Json.obj("name" -> "Olle").asOpt[Person] == None
```

### `@jsonUnwrap`

```scala
@jsonUnwrap case class Person(name: String)

Json.toJson(Person("Olle")) == JsString("Olle")
```

### `JsonMacros.unwrapFormat`

```scala
case class Person(name: String)
val format = JsonMacros.unwrapFormat[Person]

format.writes(Person("Olle")) == JsString("Olle")
```
